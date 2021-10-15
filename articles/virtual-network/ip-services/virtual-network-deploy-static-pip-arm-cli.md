---
title: Erstellen eines virtuellen Computers mit einer statischen öffentlichen IP-Adresse – Azure CLI
titlesuffix: Azure Virtual Network
description: Erstellen Sie einen virtuellen Computer mit einer statischen öffentlichen IP-Adresse mithilfe der Azure CLI. Statische öffentliche IP-Adressen sind Adressen, die sich nie ändern.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 6ea60d6b2784972f2a4e3210ad815c4823a27a19
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368235"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Erstellen einer VM mit einer statischen öffentlichen IP-Adresse mithilfe der Azure CLI

In diesem Artikel erstellen Sie einen virtuellen Computer mit einer statischen öffentlichen IP-Adresse. Eine öffentliche IP-Adresse ermöglicht die Kommunikation mit einem virtuellen Computer über das Internet. Damit sichergestellt ist, dass sich die Adresse niemals ändert, weisen Sie anstelle einer dynamischen Adresse eine statische öffentliche IP-Adresse zu. 

Für öffentliche IP-Adressen fällt eine [geringe Gebühr](https://azure.microsoft.com/pricing/details/ip-addresses) an. Die Anzahl der öffentlichen IP-Adressen, die pro Abonnement verwendet werden können, ist [begrenzt](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Für dieses Tutorial ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe namens **myResourceGroup** am Speicherort **eastus2**.

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse

Verwenden Sie [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create), um eine öffentliche Standard-IPv4-Adresse zu erstellen.

Mit dem folgenden Befehl wird eine zonenredundante öffentliche IP-Adresse mit dem Namen **myPublicIP** in **myResourceGroup** erstellt.

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --version IPv4 \
    --sku Standard \
    --zone 1 2 3
```
## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie mit dem Befehl [az vm create](/cli/azure/vm#az_vm_create) einen virtuellen Computer. 

Mit dem folgenden Befehl wird ein virtueller Windows Server-Computer erstellt. Sie geben den Namen der zuvor erstellten öffentlichen IP-Adresse im Parameter **`-PublicIPAddressName`** ein. Geben Sie bei entsprechender Aufforderung einen Benutzernamen und ein Kennwort als Anmeldeinformationen für den virtuellen Computer ein:

```azurecli-interactive
  az vm create \
    --name myVM \
    --resource-group TutorVMRoutePref-rg \
    --public-ip-address myPublicIP \
    --size Standard_A2 \
    --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
    --admin-username azureuser
```

Weitere Informationen zu SKUs für öffentliche IP-Adressen finden Sie unter [SKUs für öffentliche IP-Adressen](public-ip-addresses.md#sku). Ein virtueller Computer kann dem Back-End-Pool einer Azure Load Balancer-Instanz hinzugefügt werden. Die SKU der öffentlichen IP-Adresse muss mit der SKU der öffentlichen IP-Adresse eines Lastenausgleichs übereinstimmen. Weitere Informationen finden Sie unter [Azure-Lastenausgleichsmodul](../../load-balancer/skus.md).

Zeigen Sie die zugewiesene öffentliche IP-Adresse an, und bestätigen Sie mit [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show), dass sie als statische Adresse erstellt wurde:

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --query [ipAddress,publicIpAllocationMethod,sku] \
    --output table
```

> [!WARNING]
> Ändern Sie die IP-Adresseinstellungen nicht innerhalb des Betriebssystems des virtuellen Computers. Öffentliche Azure-IP-Adressen sind dem Betriebssystem nicht bekannt. Auch wenn Sie dem Betriebssystem Einstellungen für private IP-Adressen hinzufügen können, wird empfohlen, dies erst nach Lesen des Artikels [Hinzufügen einer privaten IP-Adresse zu einem Betriebssystem](virtual-network-network-interface-addresses.md#private) und bei absoluter Notwendigkeit zu tun.

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe und alle enthaltenen Ressourcen nicht mehr benötigt werden, können Sie sie mit [az group delete](/cli/azure/group#az_group_delete) entfernen:

```azurecli-interactive
  az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie mehr über [öffentliche IP-Adressen](public-ip-addresses.md#public-ip-addresses) in Azure.
- Erfahren Sie mehr über alle [Einstellungen für öffentliche IP-Adressen](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Lesen Sie mehr über [private IP-Adressen](private-ip-addresses.md) und das Zuweisen einer [statischen privaten IP-Adresse](virtual-network-network-interface-addresses.md#add-ip-addresses) zu einer Azure-VM.
- Erfahren Sie mehr über das Erstellen von VMs unter [Linux](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und [Windows](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).