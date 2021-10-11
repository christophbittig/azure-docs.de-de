---
title: 'Tutorial: Konfigurieren der Routingpräferenz für einen Azure Kubernetes-Dienst – Azure-Befehlszeilenschnittstelle'
titlesuffix: Azure virtual network
description: In diesem Tutorial erfahren Sie, wie Sie die Routingpräferenz für einen Azure Kubernetes-Dienst konfigurieren.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 277f861b0816b0c7eaf0267fc80f1b15d12509d5
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368928"
---
# <a name="tutorial-configure-routing-preference-for-an-azure-kubernetes-service-using-the-azure-cli"></a>Tutorial: Konfigurieren der Routingpräferenz für einen Azure Kubernetes-Dienst mithilfe der Azure-Befehlszeilenschnittstelle

In diesem Artikel erfahren Sie, wie Sie mithilfe der Azure CLI die Routingpräferenz über das ISP-Netzwerk (Option **Internet**) für einen Kubernetes-Cluster konfigurieren. Die Routingpräferenz wird festgelegt, indem eine öffentliche IP-Adresse für den Routingpräferenztyp **Internet** erstellt und beim Erstellen des AKS-Clusters verwendet wird.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer öffentlichen IP-Adresse mit der Routingpräferenz **Internet**
> * Erstellen eines Azure Kubernetes-Clusters mit der Routingpräferenz **Internet** für öffentliche IP-Adressen

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Für diesen Artikel ist mindestens Version 2.0.49 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe in der Azure-Region **USA, Osten** erstellt:

```azurecli-interactive
  az group create \
    --name TutorAKSRP-rg \
    --location eastus

```

## <a name="create-public-ip-with-internet-routing-preference"></a>Erstellen einer öffentlichen IP-Adresse mit der Routingpräferenz „Internet“

Sie erstellen eine öffentliche IP-Adresse mit einer Routingpräferenz vom Typ **Internet** mit dem Befehl [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create).

Mit dem folgenden Befehl wird eine neue öffentliche IP-Adresse mit dem Routingpräferenztyp **Internet** in der Azure-Region **USA, Osten** erstellt.

```azurecli-interactive
  az network public-ip create \
    --resource-group TutorAKSRP-rg \
    --name myPublicIP-IR \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```
> [!NOTE]
>  Die Routingpräferenz unterstützt derzeit nur öffentliche IPv4-IP-Adressen.

## <a name="create-kubernetes-cluster-with-public-ip"></a>Erstellen eines Kubernetes-Clusters mit einer öffentlichen IP-Adresse

Speichern Sie die ID der zuvor erstellten öffentlichen IP-Adresse zur späteren Verwendung in einer Variable. Rufen Sie mithilfe von [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) die ID der öffentlichen IP-Adresse ab.

Mit dem folgenden Befehl rufen Sie die ID der öffentlichen IP-Adresse ab und platzieren sie in einer Variable, die im nächsten Befehl verwendet werden soll.

```azurecli-interactive
  export resourceid=$(az network public-ip show \
    --resource-group TutorAKSRP-rg \
    --name myPublicIP-IR \
    --query id \
    --output tsv)
```

Verwenden Sie zum Erstellen des Kubernetes-Clusters [az aks create](/cli/azure/aks#az_aks_create).

Der folgende Befehl erstellt den Kubernetes-Cluster und verwendet dabei die Variable für die öffentliche IP-Adresse, die im vorherigen Schritt erstellt wurde.

```azurecli-interactive
  az aks create \
    --resource-group TutorAKSRP-rg \
    --name MyAKSCluster \
    --load-balancer-outbound-ips $resourceid \
    --generate-ssh-key
```

>[!NOTE]
>Das Bereitstellen des AKS-Clusters dauert einige Minuten.

Suchen Sie zum Überprüfen die im vorherigen Schritt im Azure-Portal erstellte öffentliche IP-Adresse. Die öffentliche IP-Adresse ist dem Lastenausgleich zugeordnet. Der Lastenausgleich ist dem Kubernetes-Cluster zugeordnet, wie unten dargestellt:

  :::image type="content" source="./media/routing-preference-azure-kubernetes-service-cli/verify-aks-ip.png" alt-text="Screenshot der öffentlichen IP-Adresse des AKS-Clusters":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Mit dem Befehl [az group delete](/cli/azure/group#az_group_delete) können Sie die Ressourcengruppe, die öffentlichen IP-Adresse, den AKS-Cluster und alle zugehörigen Ressourcen entfernen, wenn sie nicht mehr benötigt werden.

```azurecli-interactive
  az group delete \
    --name TutorAKSRP-rg
```

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie eine VM mit gemischter Routingpräferenz erstellen:
> [!div class="nextstepaction"]
> [Konfigurieren der beiden Routingpräferenzoptionen für einen virtuellen Computer](routing-preference-mixed-network-adapter-portal.md)