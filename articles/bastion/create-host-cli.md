---
title: Erstellen eines Bastion-Host über die Azure CLI | Azure Bastion
description: Hier erfahren Sie, wie Sie einen Bastion-Host per Azure CLI erstellen und löschen.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/22/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: d855327b67d9918b62ab451259dd2c7025c885b9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087151"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Erstellen eines Azure Bastion-Hosts über die Azure CLI

In diesem Artikel wird gezeigt, wie Sie über die Azure CLI einen Azure Bastion-Host erstellen. Wenn Sie den Azure Bastion-Dienst in Ihrem virtuellen Netzwerk bereitgestellt haben, steht die nahtlose RDP-/SSH-Funktion für alle virtuellen Computer im selben virtuellen Netzwerk zur Verfügung. Die Bereitstellung von Azure Bastion erfolgt pro virtuellem Netzwerk und nicht pro Abonnement/Konto oder virtuellem Computer.

Optional können Sie einen Azure Bastion-Host mithilfe folgender Methoden erstellen:
* [Azure portal](./tutorial-create-host-portal.md)
* [Azure PowerShell](bastion-create-host-powershell.md)

[!INCLUDE [About skus](../../includes/bastion-sku-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie über ein Azure-Abonnement verfügen. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial) registrieren.

[!INCLUDE [Cloud Shell CLI](../../includes/vpn-gateway-cloud-shell-cli.md)]

 > [!NOTE]
 > Die Verwendung von Azure Bastion in Verbindung mit privaten Azure DNS-Zonen wird derzeit nicht unterstützt. Stellen Sie zunächst sicher, dass das virtuelle Netzwerk, in dem Sie Ihre Bastion-Ressource bereitstellen möchten, nicht mit einer privaten DNS-Zone verknüpft ist.
 >

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Erstellen eines Bastion-Hosts

In diesem Abschnitt erfahren Sie, wie Sie eine neue Azure Bastion-Ressource über die Azure CLI erstellen.

> [!NOTE]
> Verwenden Sie bei jedem Befehl den Parameter `--location` mit `--resource-group`, um sicherzustellen, dass die Ressourcen gemeinsam bereitgestellt werden (wie in den Beispielen gezeigt).

1. Erstellen Sie ein virtuelles Netzwerk und ein Azure Bastion-Subnetz. Sie müssen ein Azure Bastion-Subnetz mit dem Namen **AzureBastionSubnet** erstellen. Dadurch weiß Azure, in welchem Subnetz die Bastion-Ressourcen bereitgestellt werden sollen. Dieses Subnetz unterscheidet sich von einem VPN-Gatewaysubnetz.

   [!INCLUDE [Note about BastionSubnet size.](../../includes/bastion-subnet-size.md)]

   ```azurecli-interactive
   az network vnet create --resource-group MyResourceGroup --name MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet --subnet-prefix 10.0.0.0/24 --location northeurope
   ```

2. Erstellen Sie eine öffentliche IP-Adresse für Azure Bastion. Die öffentliche IP-Adresse ist die öffentliche IP-Adresse der Bastion-Ressource für den RDP-/SSH-Zugriff (über den Port 443). Die öffentliche IP-Adresse muss sich in der gleichen Region befinden wie die Bastion-Ressource, die Sie erstellen.

   ```azurecli-interactive
   az network public-ip create --resource-group MyResourceGroup --name MyIp --sku Standard --location northeurope
   ```

3. Erstellen Sie eine neue Azure Bastion-Ressource im AzureBastionSubnet des virtuellen Netzwerks. Die Erstellung und Bereitstellung der Bastion-Ressource dauert etwa fünf Minuten.

   ```azurecli-interactive
   az network bastion create --name MyBastion --public-ip-address MyIp --resource-group MyResourceGroup --vnet-name MyVnet --location northeurope
   ```
## <a name="disassociate-the-vm-public-ip-address"></a>Aufheben der Zuordnung der öffentlichen IP-Adresse einer VM

Azure Bastion verwendet nicht die öffentliche IP-Adresse, um eine Verbindung mit dem virtuellen Clientcomputer herzustellen. Wenn Sie die öffentliche IP-Adresse für Ihre VM nicht benötigen, können Sie die Zuordnung dieser Adresse aufheben, indem Sie die Schritte im Artikel [Trennen einer öffentlichen IP-Adresse von einem virtuellen Azure-Computer](../virtual-network/ip-services/remove-public-ip-address-vm.md) ausführen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Häufig gestellte Fragen zu Azure Bastion](bastion-faq.md).
* Informationen zur Verwendung von Netzwerksicherheitsgruppen mit dem Azure Bastion-Subnetz finden Sie unter [Arbeiten mit Netzwerksicherheitsgruppen](bastion-nsg.md).
