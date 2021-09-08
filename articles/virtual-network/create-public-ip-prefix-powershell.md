---
title: 'Erstellen eines Präfixes für öffentliche IP-Adressen: PowerShell'
titlesuffix: Azure Virtual Network
description: Erfahren Sie, wie Sie mit PowerShell ein Präfix für eine öffentliche IP-Adresse erstellen.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 07/12/2021
ms.author: allensu
ms.openlocfilehash: 9e3c939574dcbccc775de3db8330a33a21ae4a6a
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2021
ms.locfileid: "113667271"
---
# <a name="create-a-public-ip-address-prefix-using-powershell"></a>Erstellen eines Präfixes für öffentliche IP-Adressen mit PowerShell

Sie erhalten Informationen über Präfixe öffentlicher IP-Adressen und darüber, wie Sie diese erstellen, ändern und löschen können. Das Präfix einer öffentlichen IP-Adresse ist ein zusammenhängender Bereich öffentlicher IP-Adressen mit Standard-SKU. 

Wenn Sie eine öffentliche IP-Adressressource erstellen, können Sie eine statische öffentliche IP-Adresse aus dem Präfix zuweisen und virtuellen Computern, Lastenausgleichsmodulen oder anderen Ressourcen zuordnen. Weitere Informationen finden Sie in der [Übersicht über Präfixe für öffentliche IP-Adressen](public-ip-address-prefix.md).

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

## <a name="create-a-public-ip-address-prefix"></a>Erstellen des Präfix einer öffentlichen IP-Adresse

In diesem Abschnitt erstellen Sie mit Azure PowerShell ein zonenredundantes, zonenbasiertes und nicht zonenbasiertes öffentliches IP-Adressenpräfix. 

Die Präfixe in den Beispielen sind wie folgt:

* **IPv4**: /28 (16 Adressen)

* **IPv6**: /124 (16 Adressen)

Weitere Informationen zu verfügbaren Präfixgrößen finden Sie unter [Präfixgrößen](public-ip-address-prefix.md#prefix-sizes).

Erstellen Sie mit [New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix) ein Präfix für öffentliche IP-Adressen mit dem Namen **myPublicIpPrefix** in der Region **eastus2**.

### <a name="zone-redundant-ipv4-prefix"></a>Zonenredundantes IPv4-Präfix

Um ein IPv4-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv4** in den Parameter **IpAddressVersion** ein. Um ein zonenredundantes IPv4-Präfix zu erstellen, geben Sie **1,2,3** in den Parameter **Zone** ein.

```azurepowershell-interactive
$ipv4 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    PrefixLength = '28'
    IpAddressVersion = 'IPv4'
    Zone = 1,2,3
}
New-AzPublicIpPrefix @ipv4
```

### <a name="zonal-ipv4-prefix"></a>Zonenbasiertes IPv4-Präfix

Um ein IPv4-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv4** in den Parameter **IpAddressVersion** ein. Geben Sie im Parameter **Zone** den Wert **2** ein, um ein zonenbasiertes Präfix für IP-Adressen in Zone 2 zu erstellen.

```azurepowershell-interactive
$ipv4 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    PrefixLength = '28'
    IpAddressVersion = 'IPv4'
    Zone = 2
}
New-AzPublicIpPrefix @ipv4
```

>[!NOTE]
>Die oben genannten Optionen für Zonen sind nur in Regionen mit [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) gültig.

### <a name="non-zonal-ipv4-prefix"></a>Nicht zonenbasiertes IPv4-Präfix

Um ein IPv4-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv4** in den Parameter **IpAddressVersion** ein. Entfernen Sie den Parameter **Zone**, um ein nicht zonenbasiertes IP-Präfix zu erstellen.

```azurepowershell-interactive
$ipv4 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    PrefixLength = '28'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpPrefix @ipv4
```

Das Entfernen des Parameters **Zone** im Befehl ist für alle Regionen gültig.  

Das Entfernen des Parameters **Zone** ist die Standardauswahl für standardmäßige öffentliche IP-Adressen in Regionen ohne [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

### <a name="zone-redundant-ipv6-prefix"></a>Zonenredundantes IPv6-Präfix

Um ein IPv6-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv6** in den Parameter **IpAddressVersion** ein. Um ein zonenredundantes IPv6-Präfix zu erstellen, geben Sie **1,2,3** in den Parameter **Zone** ein.

```azurepowershell-interactive
$ipv6 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    PrefixLength = '124'
    IpAddressVersion = 'IPv6'
    Zone = 1,2,3
}
New-AzPublicIpPrefix @ipv6
```

### <a name="zonal-ipv6-prefix"></a>Zonenbasiertes IPv6-Präfix

Um ein IPv6-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv6** in den Parameter **IpAddressVersion** ein. Geben Sie im Parameter **Zone** den Wert **2** ein, um ein zonenbasiertes Präfix für IP-Adressen in Zone 2 zu erstellen.

```azurepowershell-interactive
$ipv6 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    PrefixLength = '124'
    IpAddressVersion = 'IPv6'
    Zone = 2
}
New-AzPublicIpPrefix @ipv6
```

>[!NOTE]
>Die oben genannten Optionen für Zonen sind nur in Regionen mit [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) gültig.

### <a name="non-zonal-ipv6-prefix"></a>Nicht zonenbasiertes IPv6-Präfix

Um ein IPv6-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv6** in den Parameter **IpAddressVersion** ein. Entfernen Sie den Parameter **Zone**, um ein nicht zonenbasiertes IP-Präfix zu erstellen.

```azurepowershell-interactive
$ipv6 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    PrefixLength = '124'
    IpAddressVersion = 'IPv6'
}
New-AzPublicIpPrefix @ipv6
```

Das Entfernen des Parameters **Zone** im Befehl ist für alle Regionen gültig.  

Das Entfernen des Parameters **Zone** ist die Standardauswahl für standardmäßige öffentliche IP-Adressen in Regionen ohne [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Erstellen einer statischen öffentlichen IP-Adresse aus einem Präfix

Sobald Sie ein Präfix erstellt haben, müssen Sie statische IP-Adressen aus dem Präfix erstellen. In diesem Abschnitt erstellen Sie eine statische IP-Adresse aus dem Präfix, das Sie zuvor erstellt haben.

Erstellen Sie mit [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) eine öffentliche IP-Adresse im Präfix **myPublicIpPrefix**.

### <a name="ipv4-address"></a>IPv4-Adresse

Um eine öffentliche IPv4-IP-Adresse zu erstellen, geben Sie **IPv4** in den Parameter **IpAddressVersion** ein.

```azurepowershell-interactive
$pf =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
}
$prefix = Get-AzPublicIpPrefix @pf

$ipv4 =@{
    Name = 'myPublicIpAddress'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    Tier = 'Regional'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    PublicIpPrefix = $prefix
}
New-AzPublicIpAddress @ipv4
```

### <a name="ipv6-address"></a>IPv6-Adresse

Um ein IPv6-Präfix für öffentliche IP-Adressen zu erstellen, geben Sie **IPv6** in den Parameter **IpAddressVersion** ein.
```azurepowershell-interactive
$pf =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
}
$prefix = Get-AzPublicIpPrefix @pf

$ipv6 =@{
    Name = 'myPublicIpAddress'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    Tier = 'Regional'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv6'
    PublicIpPrefix = $prefix
}
New-AzPublicIpAddress @ipv6
```

>[!NOTE]
>Nur statische öffentliche IP-Adressen, die mit der Standard-SKU erstellt wurden, können aus dem Bereich des Präfix zugewiesen werden. Weitere Informationen zu SKUs für öffentliche IP-Adressen finden Sie unter [Öffentliche IP-Adressen](./public-ip-addresses.md#public-ip-addresses).

## <a name="delete-a-prefix"></a>Löschen eines Präfixes

In diesem Abschnitt erfahren Sie, wie Sie ein Präfix löschen können.

Um ein Präfix für öffentliche IP-Adressen zu löschen, verwenden Sie [Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix).

```azurepowershell-interactive
$pr =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
}
Remove-AzPublicIpPrefix @pr
```

>[!NOTE]
>Wenn Adressen innerhalb des Präfix mit öffentlichen IP-Adressressourcen verknüpft sind, müssen Sie zuerst die öffentlichen IP-Adressressourcen löschen. Informationen dazu finden Sie unter [Löschen einer öffentlichen IP-Adresse](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Artikel haben Sie ein Präfix für öffentliche IP-Adressen und eine öffentliche IP-Adresse aus diesem Präfix erstellt. 

Wenn Sie die Bearbeitung des Präfixes für öffentliche IP-Adressen abgeschlossen haben, löschen Sie die Ressourcengruppe und alle darin enthaltenen Ressourcen:

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName 'myResourceGroup'
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über Szenarien und Vorteile der Verwendung eines [Präfixes einer öffentlichen IP-Adresse](public-ip-address-prefix.md).
