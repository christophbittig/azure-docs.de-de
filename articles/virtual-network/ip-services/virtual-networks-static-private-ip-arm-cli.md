---
title: 'Erstellen eines virtuellen Computers mit einer statischen privaten IP-Adresse: Azure CLI'
description: Erfahren Sie, wie Sie eine VM mit einer statischen privaten IP-Adresse mithilfe der Azure CLI erstellen.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 900010fd454e356b43eb7cb5b2ee0379bdf90beb
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368026"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-the-azure-cli"></a>Erstellen einer VM mit einer statischen privaten IP-Adresse mithilfe der Azure CLI

Einem virtuellen Computer (VM) wird automatisch eine private IP-Adresse aus einem Bereich zugewiesen, den Sie angeben. Dieser Bereich basiert auf dem Subnetz, in dem der virtuelle Computer bereitgestellt wird. Der virtuelle Computer behält die Adresse bei, bis er gelöscht wird. Azure weist dynamisch die nächste verfügbare private IP-Adresse aus dem Subnetz zu, in dem Sie einen virtuellen Computer erstellen. Weisen Sie der VM eine statische IP-Adresse zu, wenn Sie eine bestimmte IP-Adresse im Subnetz wünschen.

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

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie mit dem Befehl [az vm create](/cli/azure/vm#az_vm_create) einen virtuellen Computer. 

Mit dem folgenden Befehl wird ein virtueller Windows Server-Computer erstellt. Geben Sie bei entsprechender Aufforderung einen Benutzernamen und ein Kennwort als Anmeldeinformationen für den virtuellen Computer ein:

```azurecli-interactive
  az vm create \
    --name myVM \
    --resource-group myResourceGroup \
    --public-ip-address myPublicIP \
    --public-ip-sku Standard \
    --size Standard_A2 \
    --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
    --admin-username azureuser
```

## <a name="change-private-ip-address-to-static"></a>Ändern der privaten IP-Adresse in statisch

In diesem Abschnitt ändern Sie die private IP-Adresse für den zuvor erstellten virtuellen Computer von **dynamisch** in **statisch**. 

Verwenden Sie [az network nic ip-config update](/cli/azure/network/nic/ipconfig#az_network_nic_ip_config_update), um die Konfiguration der Netzwerkschnittstelle zu aktualisieren.

Mit dem folgenden Befehl wird die private IP-Adresse des virtuellen Computers in statisch geändert:

```azurecli-interactive
  az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --private-ip-address 10.0.0.4
```

> [!WARNING]
> Sie können dem Betriebssystem zwar Einstellungen für private IP-Adressen hinzufügen, dies wird jedoch erst nach dem Lesen des Artikels [Hinzufügen einer privaten IP-Adresse zu einem Betriebssystem](virtual-network-network-interface-addresses.md#private) empfohlen.

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