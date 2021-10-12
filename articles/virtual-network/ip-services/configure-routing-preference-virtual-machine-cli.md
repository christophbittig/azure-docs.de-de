---
title: 'Tutorial: Konfigurieren der Routingpräferenz für eine VM – Azure CLI'
description: In diesem Tutorial erfahren Sie, wie Sie mit der Azure CLI eine VM mit öffentlicher IP-Adresse und Routingpräferenz erstellen.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 9428d28eabde40dec7ed2805217fb655d8b0f693
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369413"
---
# <a name="tutorial-configure-routing-preference-for-a-vm-using-the-azure-cli"></a>Tutorial: Konfigurieren der Routingpräferenz für einen virtuellen Computer mit der Azure CLI
In diesem Tutorial erfahren Sie, wie Sie die Routingpräferenz für eine VM konfigurieren. Der Internetdatenverkehr von der VM wird über das ISP-Netzwerk geleitet, wenn Sie **Internet** als Option für die Routingpräferenz auswählen. Das Standardrouting erfolgt über das globale Netzwerk von Microsoft.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer öffentlichen IP-Adresse mit der Routingpräferenz **Internet**
> * Erstellen Sie eine VM.
> * Überprüfen, ob für die öffentliche IP-Adresse die Routingpräferenz **Internet** festgelegt wurde

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Für dieses Tutorial ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe mit dem Namen **TutorVMRoutePref-rg** am Standort **westus2**.

```azurecli-interactive
  az group create \
    --name TutorVMRoutePref-rg \
    --location westus2
```

## <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse

Verwenden Sie [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create), um in **TutorVMRoutePref-rg** eine zonenredundante öffentliche IPv4-Standardadresse mit dem Namen **myPublicIP** zu erstellen. Das **Tag** für **Internet** wird als Parameter im CLI-Befehl auf die öffentliche IP-Adresse angewendet, um die Routingpräferenz **Internet** zu aktivieren.

```azurecli-interactive
az network public-ip create \
    --resource-group TutorVMRoutePref-rg \
    --name myPublicIP \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```

## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

Verwenden Sie [az vm create](/cli/azure/vm#az_vm_create), um einen virtuellen Computer zu erstellen. Die im vorherigen Abschnitt erstellte öffentliche IP-Adresse wird als Teil des CLI-Befehls hinzugefügt und während der Erstellung an die VM angefügt.

```azurecli-interactive
az vm create \
--name myVM \
--resource-group TutorVMRoutePref-rg \
--public-ip-address myPublicIP \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username azureuser
```

## <a name="verify-internet-routing-preference"></a>Überprüfen der Routingpräferenz „Internet“

Verwenden Sie [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show), um zu überprüfen, ob die Routingpräferenz **Internet** für die öffentliche IP-Adresse konfiguriert wurde.

```azurecli-interactive
az network public-ip show \
    --resource-group TutorVMRoutePref-rg \
    --name myPublicIP \
    --query ipTags \
    --output tsv
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nachdem Sie die VM und die öffentliche IP-Adresse bearbeitet haben, löschen Sie mit [az group delete](/cli/azure/group#az_group_delete) die Ressourcengruppe und alle darin enthaltenen Ressourcen.

```azurecli-interactive
  az group delete \
    --name TutorVMRoutePref-rg
```

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie eine VM mit gemischter Routingpräferenz erstellen:
> [!div class="nextstepaction"]
> [Konfigurieren der beiden Routingpräferenzoptionen für einen virtuellen Computer](routing-preference-mixed-network-adapter-portal.md)

