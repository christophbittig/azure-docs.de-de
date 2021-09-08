---
title: Konfigurieren der Routingpräferenz für einen Azure Kubernetes-Dienst mithilfe der Azure CLI
titlesuffix: Azure Virtual Network
description: Erfahren Sie, wie Sie mit der Azure CLI einen AKS-Cluster mit Routingpräferenz konfigurieren.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 491572ceaac3e4bc401d5bd6c3a5401966d0c528
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463624"
---
# <a name="configure-routing-preference-for-a-kubernetes-cluster-using-azure-cli"></a>Konfigurieren der Routingpräferenz für einen Kubernetes-Cluster mithilfe der Azure CLI

In diesem Artikel erfahren Sie, wie Sie mithilfe der Azure CLI die Routingpräferenz über das ISP-Netzwerk (Option **Internet**) für einen Kubernetes-Cluster konfigurieren. Die Routingpräferenz wird festgelegt, indem eine öffentliche IP-Adresse für den Routingpräferenztyp **Internet** erstellt und beim Erstellen des AKS-Clusters verwendet wird.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.49 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe in der Azure-Region **USA, Osten** erstellt:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse

Erstellen Sie eine öffentliche IP-Adresse mit Routingpräferenz vom Typ **Internet** mit dem Befehl [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create).

Mit dem folgenden Befehl wird eine neue öffentliche IP-Adresse mit dem Routingpräferenztyp **Internet** in der Azure-Region **USA, Osten** erstellt.

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```
> [!NOTE]
>  Die Routingpräferenz unterstützt derzeit nur öffentliche IPv4-IP-Adressen.

## <a name="get-the-id-of-public-ip-address"></a>Abrufen der ID der öffentlichen IP-Adresse

Mit dem folgenden Befehl wird die ID der öffentlichen IP-Adresse zurückgegeben, die im vorherigen Abschnitt erstellt wurde:
```azurecli
az network public-ip show \
--resource-group myResourceGroup \
--name myRoutingPrefIP \
--query id
```
## <a name="create-kubernetes-cluster-with-the-public-ip"></a>Erstellen eines Kubernetes-Clusters mit der öffentlichen IP-Adresse

Mit dem folgenden Befehl wird der AKS-Cluster mit der öffentlichen IP-Adresse erstellt, die im vorherigen Abschnitt erstellt wurde:

```azurecli
az aks create \
--resource-group MyResourceGroup \
--name MyAKSCluster \
--load-balancer-outbound-ips "Enter the public IP ID from previous step" --generate-ssh-key
```

>[!NOTE]
>Das Bereitstellen des AKS-Clusters dauert einige Minuten.

Suchen Sie zum Überprüfen die im vorherigen Abschnitt im Azure-Portal erstellte öffentliche IP-Adresse. Die IP-Adresse ist dem Lastenausgleich zugeordnet, der dem Kubernetes-Cluster zugeordnet ist, wie unten dargestellt:

 ![Öffentliche IP-Adresse für Kubernetes mit Routingpräferenz](./media/routing-preference-azure-kubernetes-service-cli/routing-preference-azure-kubernetes-service.png)


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Routingpräferenz in öffentlichen IP-Adressen](routing-preference-overview.md). 
- [Konfigurieren der Routingpräferenz für einen virtuellen Computer mithilfe der Azure CLI](configure-routing-preference-virtual-machine-cli.md)
