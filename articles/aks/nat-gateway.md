---
title: Managed NAT Gateway (Vorschau)
description: Erfahren Sie, wie Sie einen AKS-Cluster mit Managed NAT-Integration erstellen.
services: container-service
ms.topic: article
ms.date: 10/26/2021
ms.author: juda
ms.openlocfilehash: 87edce54391661fe986365ad45d72bfeee41c761
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179596"
---
# <a name="managed-nat-gateway-preview"></a>Managed NAT Gateway (Vorschau)

Wenngleich AKS-Kunden in der Lage sind, ausgehenden Datenverkehr durch einen Azure Load Balancer zu leiten, gelten Einschränkungen hinsichtlich der Menge des ausgehenden Datenverkehrs. 

Azure NAT Gateway lässt bis zu 64.000 ausgehende UDP- und TCP-Datenverkehrsflüsse pro IP-Adresse mit maximal 16 IP-Adressen zu.

In diesem Artikel erfahren Sie, wie Sie einen AKS-Cluster mit einer Managed NAT Gateway-Instanz für ausgehenden Datenverkehr erstellen.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Vorbereitung

Um eine Managed NAT Gateway-Instanz verwenden zu können, benötigen Sie Folgendes:

* Die aktuelle Version der Azure CLI
* Erweiterung `aks-preview`, Version 0.5.31 oder höher
* Kubernetes, Version 1.20.x oder höher


### <a name="register-the-aks-natgatewaypreview-feature-flag"></a>Registrieren des `AKS-NATGatewayPreview`-Featureflags

Wenn Sie das NAT Gateway-Feature nutzen möchten, müssen Sie das Featureflag `AKS-NATGatewayPreview` für Ihr Abonnement aktivieren. 

```azurecli
az feature register --namespace "Microsoft.ContainerService" --name "AKS-NATGatewayPreview"
```
Sie können den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list] überprüfen:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-NATGatewayPreview')].{Name:name,State:properties.state}"
```

Wenn der Vorgang abgeschlossen ist, können Sie die Registrierung des *Microsoft.ContainerService*-Ressourcenanbieters mit dem Befehl [az provider register][az-provider-register] aktualisieren:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```


## <a name="create-an-aks-cluster-with-a-managed-nat-gateway"></a>Erstellen eines AKS-Clusters mit einer Managed NAT Gateway-Instanz
Um einen AKS-Cluster mit einer neuen Managed NAT Gateway-Instanz zu erstellen, verwenden Sie `--outbound-type managedNATGateway` sowie `--nat-gateway-managed-outbound-ip-count` und `--nat-gateway-idle-timeout`, wenn Sie `az aks create` ausführen. Über das folgende Beispiel werden die Ressourcengruppe *myresourcegroup* und anschließend ein AKS-Cluster namens *natcluster* in *myresourcegroup* erstellt und einer Managed NAT Gateway-Instanz, zwei ausgehenden IP-Adressen und einer Leerlaufzeit von 30 Sekunden konfiguriert.


```azurecli-interactive
az group create --name myresourcegroup --location southcentralus
```

```azurecli-interactive
az aks create \
    --resource-group myresourcegroup \
    --name natcluster \
    --node-count 3 \
    --outbound-type managedNATGateway \ 
    --nat-gateway-managed-outbound-ip-count 2 \
    --nat-gateway-idle-timeout 30
```

> [!IMPORTANT]
> Wenn kein Wert für die Anzahl ausgehender IP-Adressen angegeben wird, lautet der Standardwert 1.

### <a name="update-the-number-of-outbound-ip-addresses"></a>Aktualisieren der Anzahl ausgehender IP-Adressen
Um die ausgehende IP-Adresse oder die Leerlaufzeit zu aktualisieren, verwenden Sie `--nat-gateway-managed-outbound-ip-count` oder `--nat-gateway-idle-timeout`, wenn Sie `az aks update` ausführen. Zum Beispiel:

```azurecli-interactive
az aks update \ 
    --resource-group myresourcegroup \
    --name natcluster\
    --nat-gateway-managed-outbound-ip-count 5
```

## <a name="create-an-aks-cluster-with-a-user-assigned-nat-gateway"></a>Erstellen eines AKS-Clusters mit einem vom Benutzer zugewiesenen NAT Gateway
Um einen AKS-Cluster mit einem vom Benutzer zugewiesenen NAT Gateway zu erstellen, verwenden Sie `--outbound-type userAssignedNATGateway`, wenn Sie `az aks create` ausführen. Diese Konfiguration setzt voraus, dass Sie Ihr eigenes Netzwerk mitbringen (über [Kubenet][byo-vnet-kubenet] oder [Azure CNI][byo-vnet-azure-cni]) und dass das NAT Gateway im Subnetz vorkonfiguriert ist. Die folgenden Befehle erstellen die erforderlichen Ressourcen für dieses Szenario. Achten Sie darauf, dass Sie sie alle in derselben Sitzung ausführen, damit die in den Variablen gespeicherten Werte für den Befehl `az aks create` weiterhin verfügbar sind.

1. Erstellen Sie die Ressourcengruppe:
    ```azurecli-interactive
    az group create --name myresourcegroup \
        --location southcentralus
    ```

2. Erstellen Sie eine verwaltete Identität für Netzwerkberechtigungen und speichern Sie die ID in `$IDENTITY_ID` zur späteren Verwendung:
    ```azurecli-interactive
    IDENTITY_ID=$(az identity create \
        --resource-group myresourcegroup \
        --name natclusterid \
        --location southcentralus \
        --query id \
        --output tsv)
    ```

3. Erstellen Sie eine öffentliche IP für das NAT-Gateway:
    ```azurecli-interactive
    az network public-ip create \
        --resource-group myresourcegroup \
        --name mynatgatewaypip \
        --location southcentralus \
        --sku standard
    ```

4. Erstellen Sie das NAT-Gateway:
    ```azurecli-interactive
    az network nat gateway create \
        --resource-group myresourcegroup \
        --name mynatgateway \
        --location southcentralus \
        --public-ip-addresses mynatgatewaypip
    ```

5. Erstellen Sie ein virtuelles Netzwerk:
    ```azurecli-interactive
    az network vnet create \
        --resource-group myresourcegroup \
        --name myvnet \
        --location southcentralus \
        --address-prefixes 172.16.0.0/20 
    ```

6. Erstellen Sie ein Subnetz im virtuellen Netzwerk mit Hilfe des NAT-Gateways und speichern Sie die ID auf `$SUBNET_ID` zur späteren Verwendung:
    ```azurecli-interactive
    SUBNET_ID=$(az network vnet subnet create \
        --resource-group myresourcegroup \
        --vnet-name myvnet \
        --name natcluster \
        --address-prefixes 172.16.0.0/22 \
        --nat-gateway mynatgateway \
        --query id \
        --output tsv)
    ```

7. Erstellen Sie einen AKS-Cluster unter Verwendung des Subnetzes mit dem NAT-Gateway und der verwalteten Identität:
    ```azurecli-interactive
    az aks create \
        --resource-group myresourcegroup \
        --name natcluster \
        --location southcentralus \
        --network-plugin azure \
        --vnet-subnet-id $SUBNET_ID \
        --outbound-type userAssignedNATGateway \
        --enable-managed-identity \
        --assign-identity $IDENTITY_ID
    ```

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu Azure NAT Gateway finden Sie unter [Azure NAT Gateway][nat-docs].

<!-- LINKS - internal -->


<!-- LINKS - external-->
[nat-docs]: ../virtual-network/nat-gateway/nat-overview.md
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[byo-vnet-azure-cni]: configure-azure-cni.md
[byo-vnet-kubenet]: configure-kubenet.md