---
title: 'Schnellstart: Erstellen einer öffentlichen IP-Adresse – PowerShell'
titleSuffix: Azure Virtual Network
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Azure PowerShell eine öffentliche IP-Adresse erstellen.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 51a59615d0f87943e195a3f189d96eccaf67623f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369423"
---
# <a name="quickstart-create-a-public-ip-address-using-powershell"></a>Schnellstart: Erstellen einer öffentlichen IP-Adresse mithilfe von PowerShell

In dieser Schnellstartanleitung erfahren Sie, wie Sie eine öffentliche Azure-IP-Adresse erstellen. Öffentliche IP-Adressen in Azure werden für öffentliche Verbindungen mit Azure-Ressourcen verwendet. Öffentliche IP-Adressen sind in zwei SKUs verfügbar: „Basic“ und „Standard“. Zwei Ebenen öffentlicher IP-Adressen sind verfügbar: regional und global. Die Routingpräferenz einer öffentlichen IP-Adresse wird bei der Erstellung festgelegt. Die verfügbaren Optionen sind Internetrouting und Microsoft-Netzwerkrouting.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell (lokal installiert) oder Azure Cloud Shell

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 5.4.1 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe namens **QuickStartCreateIP-rg** am Standort **eastus2**.

```azurepowershell-interactive
$rg =@{
    Name = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
}
New-AzResourceGroup @rg
```
## <a name="create-public-ip"></a>Öffentliche IP-Adresse erstellen

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/create-public-ip-standard)

>[!NOTE]
>Für Produktionsworkloads wird eine öffentliche IP-Adresse der SKU „Standard“ empfohlen.  Weitere Informationen zu SKUs finden Sie unter **[Öffentliche IP-Adressen](public-ip-addresses.md)** .
>
>Der folgende Befehl funktioniert für die Az.Network-Modulversion 4.5.0 oder höher.  Weitere Informationen zu den derzeit verwendeten PowerShell-Modulen finden Sie in der [PowerShellGet-Dokumentation](/powershell/module/powershellget/).

In diesem Abschnitt erstellen Sie eine öffentliche IP-Adresse mit Zonen. Öffentliche IP-Adressen können zonenredundant oder zonengebunden sein.

Verwenden Sie [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress), um in **QuickStartCreateIP-rg** eine zonenredundante öffentliche IPv4-Adresse der SKU „Standard“ namens **myStandardPublicIP** zu erstellen. 

Ändern Sie zum Erstellen einer IPv6-Adresse den Parameter **`--IpAddressVersion`** in **IPv6**.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'QuickStartCreateIP-rg'
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

# <a name="basic-sku"></a>[**Basic-SKU**](#tab/create-public-ip-basic)

>[!NOTE]
>Für Produktionsworkloads wird eine öffentliche IP-Adresse der SKU „Standard“ empfohlen.  Weitere Informationen zu SKUs finden Sie unter **[Öffentliche IP-Adressen](public-ip-addresses.md)** .

In diesem Abschnitt erstellen Sie eine IP-Adresse der SKU „Basic“. Öffentliche IP-Adressen des Typs „Basic“ unterstützen keine Verfügbarkeitszonen.

Verwenden Sie [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress), um in **QuickStartCreateIP-rg** eine statische öffentliche IPv4-Adresse der SKU „Basic“ namens **myBasicPublicIP** zu erstellen.  

Ändern Sie zum Erstellen einer IPv6-Adresse den Parameter **`--IpAddressVersion`** in **IPv6**. 

```azurepowershell-interactive
$ip = @{
    Name = 'myBasicPublicIP'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Basic'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
Wenn es akzeptabel ist, dass sich die IP-Adresse im Laufe der Zeit ändert, kann als IP-Zuweisung **Dynamic** ausgewählt werden, indem **`-AllocationMethod`** in **Dynamic** geändert wird. 

>[!NOTE]
> Eine IPv6-Adresse der SKU „Basic“ muss immer dynamisch sein.

---

## <a name="create-a-zonal-or-no-zone-public-ip-address"></a>Erstellen einer öffentlichen zonalen oder zonenfreien IP-Adresse

In diesem Abschnitt erfahren Sie, wie Sie eine zonale oder zonenfreie öffentliche IP-Adresse erstellen.

# <a name="zonal"></a>[**Zonal**](#tab/create-public-ip-zonal)

Verwenden Sie den folgenden Befehl, um in **QuickStartCreateIP-rg** eine zonale öffentliche IPv4-Adresse der SKU „Standard“ namens **myStandardPublicIP-zonal** in Zone 2 zu erstellen.

Ändern Sie zum Erstellen einer IPv6-Adresse den Parameter **`--IpAddressVersion`** in **IPv6**.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-zonal'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 2
}
New-AzPublicIpAddress @ip
```
>[!NOTE]
>Die oben genannten Optionen für Zonen sind nur in Regionen mit [Verfügbarkeitszonen](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) gültig.

# <a name="non-zonal"></a>[**Nicht zonal**](#tab/create-public-ip-non-zonal)

In diesem Abschnitt erstellen Sie eine nicht zonengebundene IP-Adresse.  

>[!NOTE]
>Der folgende Befehl funktioniert für die Az.Network-Modulversion 4.5.0 oder höher.  Weitere Informationen zu den derzeit verwendeten PowerShell-Modulen finden Sie in der [PowerShellGet-Dokumentation](/powershell/module/powershellget/).

Verwenden Sie [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress), um in **QuickStartCreateIP-rg** eine öffentliche IPv4-Adresse der SKU „Standard“ als nicht zonale Ressource namens **myStandardPublicIP-nozone** zu erstellen. 

Ändern Sie zum Erstellen einer IPv6-Adresse den Parameter **`-IpAddressVersion`** in **IPv6**.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-nozone'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
Das Entfernen des Parameters **`-Zone`** im Befehl gilt für alle Regionen.  

Das Entfernen des Parameters **`-Zone`** ist die Standardauswahl für öffentliche IP-Adressen der SKU „Standard“ in Regionen ohne [Verfügbarkeitszonen](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

---

## <a name="routing-preference-and-tier"></a>Routingpräferenz und -ebene

Statische öffentliche IPv4-Adressen der SKU „Standard“ unterstützen die Routingpräferenz bzw. eine globale Ebene.

# <a name="routing-preference"></a>[**Routingpräferenz**](#tab/routing-preference)

Standardmäßig ist die Routingpräferenz für öffentliche IP-Adressen auf „Microsoft-Netzwerk“ festgelegt. Damit wird der Datenverkehr über das globale Microsoft-WAN an den Benutzer übertragen.  

Durch Auswahl von **Internet** wird der Datenverkehr im Microsoft-Netzwerk minimiert, anstatt den Datenverkehr über das Transit-ISP-Netzwerk zu optimierten Kosten zu übertragen.  

Weitere Informationen zu den Routingpräferenzen finden Sie unter [Was ist Routingpräferenz (Vorschau)?](routing-preference-overview.md)

Der Befehl erstellt eine neue zonenredundante öffentliche IPv4-Adresse der SKU „Standard“ mit einer Routingpräferenz vom Typ **Internet**:

```azurepowershell-interactive
## Create IP tag for Internet and Routing Preference. ##
$tag = @{
    IpTagType = 'RoutingPreference'
    Tag = 'Internet'   
}
$ipTag = New-AzPublicIpTag @tag

## Create IP. ##
$ip = @{
    Name = 'myStandardPublicIP-RP'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    IpTag = $ipTag
    Zone = 1,2,3   
}
New-AzPublicIpAddress @ip
```

# <a name="tier"></a>[**Tarif**](#tab/tier)

Öffentliche IP-Adressen sind einer einzelnen Region zugeordnet. Bei der Ebene **Global** erstreckt sich eine IP-Adresse über mehrere Regionen. Die Ebene **Global** ist für die Front-Ends des regionsübergreifenden Lastenausgleichs erforderlich.  

Weitere Informationen finden Sie unter [Regionsübergreifender Lastenausgleich](../../load-balancer/cross-region-overview.md).

Mit dem folgenden Befehl wird eine globale IPv4-Adresse erstellt. Diese Adresse kann dem Front-End eines regionsübergreifenden Lastenausgleichs zugeordnet werden.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-Global'
    ResourceGroupName = 'QuickStartCreateIP-rg'
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

---

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den virtuellen Computer und die öffentliche IP-Adresse nicht mehr benötigen, löschen Sie mit [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) die Ressourcengruppe und alle darin enthaltenen Ressourcen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'TutorVMRoutePref-rg'

```

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie ein Präfix für öffentliche IP-Adressen erstellen:
> [!div class="nextstepaction"]
> [Schnellstart: Erstellen eines Präfixes für öffentliche IP-Adressen mithilfe von PowerShell](create-public-ip-prefix-powershell.md)