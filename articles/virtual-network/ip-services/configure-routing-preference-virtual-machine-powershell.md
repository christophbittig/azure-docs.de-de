---
title: 'Tutorial: Konfigurieren der Routingpräferenz für eine VM – Azure PowerShell'
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure PowerShell eine VM mit öffentlicher IP-Adresse und Routingpräferenz erstellen.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 5442bbd177feab409daa7ed5eb5bad95aeed9e04
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368920"
---
# <a name="tutorial-configure-routing-preference-for-a-vm-using-azure-powershell"></a>Tutorial: Konfigurieren der Routingpräferenz für eine VM mithilfe von Azure PowerShell

In diesem Tutorial erfahren Sie, wie Sie die Routingpräferenz für eine VM konfigurieren. Der Internetdatenverkehr von der VM wird über das ISP-Netzwerk geleitet, wenn Sie **Internet** als Option für die Routingpräferenz auswählen. Das Standardrouting erfolgt über das globale Netzwerk von Microsoft.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer öffentlichen IP-Adresse mit der Routingpräferenz **Internet**
> * Erstellen Sie eine VM.
> * Überprüfen, ob für die öffentliche IP-Adresse die Routingpräferenz **Internet** festgelegt wurde

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell (lokal installiert) oder Azure Cloud Shell

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 5.4.1 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe namens **TutorVMRoutePref-rg** am Standort **westus2**.

```azurepowershell-interactive
New-AzResourceGroup -Name 'TutorVMRoutePref-rg' -Location 'westus2'

```

## <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse

Verwenden Sie [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress), um eine zonenredundante öffentliche IPv4-Adresse der SKU „Standard“ namens **myPublicIP** in **TutorVMRoutePref-rg** zu erstellen. Das **Tag** für **Internet** wird als Parameter im PowerShell-Befehl auf die öffentliche IP-Adresse angewandt, um die Routingpräferenz **Internet** zu aktivieren.

```azurepowershell-interactive
## Create IP tag for Internet and Routing Preference. ##
$tag = @{
    IpTagType = 'RoutingPreference'
    Tag = 'Internet'   
}
$ipTag = New-AzPublicIpTag @tag

## Create IP. ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'TutorVMRoutePref-rg'
    Location = 'westus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    IpTag = $ipTag
    Zone = 1,2,3   
}
New-AzPublicIpAddress @ip
```

## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie mit [New-AzVM](/powershell/module/az.compute/new-azvm) eine VM. Die im vorherigen Abschnitt erstellte öffentliche IP-Adresse wird als Teil des PowerShell-Befehls hinzugefügt und während der Erstellung an die VM angefügt.

```azurepowershell-interactive
## Create virtual machine. ##
$vm = @{
    ResourceGroupName = 'TutorVMRoutePref-rg'
    Location = 'West US 2'
    Name = 'myVM'
    PublicIpAddressName = 'myPublicIP'
}
New-AzVM @vm
```

## <a name="verify-internet-routing-preference"></a>Überprüfen der Routingpräferenz „Internet“

Verwenden Sie [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress), um zu überprüfen, ob die Routingpräferenz **Internet** für die öffentliche IP-Adresse konfiguriert wurde.

```azurepowershell-interactive
$ip = @{
    ResourceGroupName = 'TutorVMRoutePref-rg'
    Name = 'myPublicIP'
}  
Get-AzPublicIPAddress @ip | select -ExpandProperty IpTags

```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nachdem Sie die VM und die öffentliche IP-Adresse bearbeitet haben, löschen Sie mit [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) die Ressourcengruppe und alle darin enthaltenen Ressourcen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'TutorVMRoutePref-rg'

```

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie eine VM mit gemischter Routingpräferenz erstellen:
> [!div class="nextstepaction"]
> [Konfigurieren der beiden Routingpräferenzoptionen für einen virtuellen Computer](routing-preference-mixed-network-adapter-portal.md)

