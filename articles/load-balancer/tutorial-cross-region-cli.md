---
title: 'Tutorial: Erstellen eines regionsübergreifenden Lastenausgleichs mit der Azure CLI'
titleSuffix: Azure Load Balancer
description: In diesem Tutorial stellen Sie zunächst mit der Azure CLI eine regionsübergreifende Azure Load Balancer-Instanz bereit.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 03/04/2021
ms.openlocfilehash: b9c96a6434712ed3179fe25c1014dc1fdc6e062d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124736571"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-cli"></a>Tutorial: Erstellen einer regionsübergreifenden Azure Load Balancer-Instanz mit der Azure CLI

Ein regionsübergreifender Lastenausgleich stellt sicher, dass ein Dienst global in mehreren Azure-Regionen verfügbar ist. Wenn eine Region ausfällt, wird der Datenverkehr an den nächstgelegenen fehlerfreien Lastenausgleich weitergeleitet.  

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines regionsübergreifenden Lastenausgleichs
> * Erstellen Sie eine Load Balancer-Regel.
> * Erstellen eines Back-End-Pools mit zwei regionalen Lastenausgleichsmodulen
> * Testen des Lastenausgleichs

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement.
- Zwei Azure Load Balancer-Instanzen der SKU **Standard** mit Back-End-Pool in zwei verschiedenen Azure-Regionen
    - Informationen zum Erstellen einer regionalen Load Balancer Standard-Instanz virtueller Computer für Back-End-Pools finden Sie unter [Schnellstart: Erstellen eines öffentlichen Lastenausgleichs für virtuelle Computer mithilfe der Azure CLI](quickstart-load-balancer-standard-public-cli.md).
        - Fügen Sie den Namen der Lastenausgleichsmodule und virtuellen Computer in jeder Region mit **-R1** und **-R2** an. 
- Azure CLI (lokal installiert) oder Azure Cloud Shell

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diese Schnellstartanleitung mindestens die Azure CLI-Version 2.0.28 verwenden. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Anmelden bei der Azure-Befehlszeilenschnittstelle

Anmelden bei der Azure CLI an:

```azurecli-interactive
az login
```

## <a name="create-cross-region-load-balancer"></a>Erstellen eines regionsübergreifenden Lastenausgleichs

In diesem Abschnitt erstellen Sie einen regionsübergreifenden Lastenausgleich, eine öffentliche IP-Adresse sowie eine Lastenausgleichsregel.

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe:

* Name: **myResourceGroupLB-CR**
* Standort: **westus** (USA, Westen).

```azurecli-interactive
  az group create \
    --name myResourceGroupLB-CR \
    --location westus
```

### <a name="create-the-load-balancer-resource"></a>Erstellen der Lastenausgleichsressource

Erstellen Sie einen regionsübergreifenden Lastenausgleich mit [az network cross-region-lb create](/cli/azure/network/cross-region-lb#az_network_cross_region_lb_create):

* Name: **myLoadBalancer-CR**
* Ein Front-End-Pool namens **myFrontEnd**
* Ein Back-End-Pool namens **myBackEndPool**

```azurecli-interactive
  az network cross-region-lb create \
    --name myLoadBalancer-CR \
    --resource-group myResourceGroupLB-CR \
    --frontend-ip-name myFrontEnd-CR \
    --backend-pool-name myBackEndPool-CR     
```

### <a name="create-the-load-balancer-rule"></a>Erstellen der Lastenausgleichsregel

Durch eine Lastenausgleichsregel wird Folgendes definiert:

* Front-End-IP-Konfiguration für den eingehenden Datenverkehr.
* Back-End-IP-Pool zum Empfangen des Datenverkehrs.
* Erforderliche Quell- und Zielports. 

Erstellen Sie mit [az network lb rule create](/cli/azure/network/cross-region-lb/rule#az_network_cross_region_lb_rule_create) eine regionsübergreifende Lastenausgleichsregel:

* Name: **myHTTPRule-CR**
* Lauscht am **Port 80** im Front-End-Pool **myHTTPRule-CR**
* Sendet Netzwerkdatenverkehr, für den ein Lastenausgleich durchgeführt wurde, an den Back-End-Adresspool **myHTTPRule-CR** unter Verwendung von **Port 80** 
* Protokoll: **TCP**

```azurecli-interactive
  az network cross-region-lb rule create \
    --backend-port 80 \
    --frontend-port 80 \
    --lb-name myLoadBalancer-CR \
    --name myHTTPRule-CR \
    --protocol tcp \
    --resource-group myResourceGroupLB-CR \
    --backend-pool-name myBackEndPool-CR \
    --frontend-ip-name myFrontEnd-CR
```

## <a name="create-backend-pool"></a>Erstellen eines Back-End-Pools

In diesem Abschnitt fügen Sie dem Back-End-Pool des regionsübergreifenden Lastenausgleichs zwei regionale Load Balancer Standard-Instanzen hinzu.

> [!IMPORTANT]
> Vergewissern Sie sich vor dem Ausführen dieser Schritte, dass zwei regionale Lastenausgleichsmodule mit Back-End-Pools in Ihrem Abonnement bereitgestellt wurden.  Weitere Informationen finden Sie unter **[Schnellstart: Erstellen eines öffentlichen Lastenausgleichs für virtuelle Computer mithilfe der Azure CLI](quickstart-load-balancer-standard-public-cli.md)** .

### <a name="add-the-regional-frontends-to-load-balancer"></a>Hinzufügen der regionalen Front-Ends zum Lastenausgleich

In diesem Abschnitt platzieren Sie die Ressourcen-IDs von zwei Front-Ends der regionaler Lastenausgleiche in Variablen.  Anschließend verwenden Sie die Variablen zum Hinzufügen der Front-Ends zum Back-End-Adresspool des regionsübergreifenden Lastenausgleichs.

Rufen Sie die Ressourcen-IDs mit [az network lb frontend-ip show](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_show) ab.

Verwenden Sie [az network cross-region-lb address-pool address add](/cli/azure/network/cross-region-lb/address-pool/address#az_network_cross_region_lb_address_pool_address_add) um die Front-Ends hinzuzufügen, die Sie in Variablen im Back-End-Pool des regionsübergreifenden Lastenausgleichs platziert haben:

```azurecli-interactive
  region1id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R1 \
    --name myFrontEnd-R1 \
    --resource-group CreatePubLBQS-rg-r1 \
    --query id \
    --output tsv)

  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region1id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R1 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR

  region2id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R2 \
    --name myFrontEnd-R2 \
    --resource-group CreatePubLBQS-rg-r2 \
    --query id \
    --output tsv)
  
  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region2id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R2 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR
```

## <a name="test-the-load-balancer"></a>Testen des Lastenausgleichs

In diesem Abschnitt testen Sie den regionsübergreifenden Lastenausgleich. Sie stellen in einem Webbrowser eine Verbindung mit der öffentlichen IP-Adresse her.  Sie halten die virtuellen Computer in den Back-End-Pools eines der regionalen Lastenausgleiche an und beobachten das Failover.

1. Rufen Sie mithilfe von [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) die öffentliche IP-Adresse des Lastenausgleichs ab:

    ```azurecli-interactive
      az network public-ip show \
        --resource-group myResourceGroupLB-CR \
        --name PublicIPmyLoadBalancer-CR \
        --query ipAddress \
        --output tsv
    ```
2. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein. Die Standardseite des IIS-Webservers wird im Browser angezeigt.

3. Beenden Sie die virtuellen Computer im Back-End-Pool eines der regionalen Lastenausgleichsmodule.

4. Aktualisieren Sie den Webbrowser, und beobachten Sie das Failover der Verbindung zum anderen regionalen Lastenausgleich.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie den Befehl [az group delete](/cli/azure/group#az_group_delete), um die Ressourcengruppe, den Lastenausgleich und alle zugehörigen Ressourcen zu entfernen, wenn Sie sie nicht mehr benötigen.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB-CR
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie:

* einen regionsübergreifenden Lastenausgleich erstellt
* eine Lastenausgleichsregel erstellt
* dem Back-End-Pool des regionsübergreifenden Lastenausgleichs regionale Lastenausgleichsmodule hinzugefügt
* Testen des Lastenausgleichs

Fahren Sie mit dem nächsten Artikel fort, und lernen Sie das...
> [!div class="nextstepaction"]
> [Lastenausgleich für virtuelle Computer über Verfügbarkeitszonen hinweg](./quickstart-load-balancer-standard-public-portal.md)