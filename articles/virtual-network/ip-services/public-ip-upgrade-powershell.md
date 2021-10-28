---
title: 'Durchführen eines Upgrades für eine öffentliche IP-Adresse: Azure PowerShell'
description: In diesem Artikel erfahren Sie, wie Sie mithilfe von Azure PowerShell ein Upgrade für eine öffentliche IP-Adresse der Basic-SKU durchführen.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.custom: template-how-to
ms.openlocfilehash: bd34b38710bdf4cc3fe4d21c5dee8e1bb7773124
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254062"
---
# <a name="upgrade-a-public-ip-address-using-azure-powershell"></a>Durchführen eines Upgrades für eine öffentliche IP-Adresse mit Azure PowerShell

Öffentliche Azure-IP-Adressen werden mit einer SKU (Basic oder Standard) erstellt. Durch die SKU wird ihre Funktionalität festgelegt, darunter Zuordnungsmethode, Featureunterstützung und Ressourcen, denen sie zugeordnet werden können. 

In diesem Artikel erfahren Sie, wie Sie mithilfe von Azure PowerShell ein Upgrade einer statischen öffentlichen IP-Adresse der Basic-SKU auf die Standard-SKU durchführen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Eine **statische** öffentliche IP-Adresse der Basic-SKU in Ihrem Abonnement. Weitere Informationen finden Sie unter [Erstellen einer öffentlichen IP-Adresse im Azure-Portal](./create-public-ip-portal.md#create-a-basic-sku-public-ip-address).
* Azure PowerShell (lokal installiert) oder Azure Cloud Shell

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 5.4.1 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="upgrade-public-ip-address"></a>Durchführen eines Upgrades für eine öffentliche IP-Adresse

In diesem Abschnitt verwenden Sie die Azure CLI, um ein Upgrade Ihrer statischen öffentlichen IP-Adresse der Basic-SKU auf die Standard-SKU durchzuführen.

```azurepowershell-interactive
### Place the public IP address into a variable. ###
$ip = @{
    Name = 'myBasicPublicIP'
    ResourceGroupName = 'myResourceGroup'
}
$pubIP = Get-AzPublicIpAddress @ip

### Set the SKU to standard. ###
$pubIP.Sku.Name = 'Standard'
Set-AzPublicIpAddress -PublicIpAddress $pubIP

```
> [!NOTE]
> Die öffentliche IP-Adresse der Basic-SKU, für die ein Upgrade ausgeführt werden soll, muss den statischen Zuordnungstyp aufweisen. Wenn Sie versuchen, ein Upgrade für eine dynamisch zugeordnete IP-Adresse durchzuführen, erhalten Sie eine Warnung, dass das Upgrade der IP-Adresse nicht möglich ist.

> [!WARNING]
> Ein Upgrade einer öffentlichen IP-Adresse der Basic-SKU auf die Standard-SKU kann nicht rückgängig gemacht werden. Auch nach dem Upgrade von der Basic-SKU auf die Standard-SKU verfügen die öffentlichen IP-Adressen nicht über garantierte [Verfügbarkeitszonen](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

## <a name="verify-upgrade"></a>Verifizieren des Upgrades

In diesem Abschnitt überprüfen Sie, ob die öffentliche IP-Adresse jetzt der Standard-SKU entspricht.

```azurepowershell-interactive
### Place the public IP address into a variable. ###
$ip = @{
    Name = 'myBasicPublicIP'
    ResourceGroupName = 'myResourceGroup'
}
$pubIP = Get-AzPublicIpAddress @ip

### Display setting. ####
$pubIP.Sku.Name
```
Mit diesem Befehl sollte **Standard** angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie ein Upgrade einer öffentlichen IP-Adresse der Basic-SKU auf eine Standard-SKU durchgeführt.

Weitere Informationen zu öffentlichen IP-Adressen in Azure finden Sie unter:

- [Öffentliche IP-Adressen in Azure](public-ip-addresses.md)
- [Erstellen einer öffentlichen IP-Adresse: Azure-Portal](./create-public-ip-portal.md)