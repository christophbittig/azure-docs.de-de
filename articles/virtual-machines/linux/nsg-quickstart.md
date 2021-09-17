---
title: Öffnen von Ports für eine VM über die Azure-Befehlszeilenschnittstelle
description: Erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle für Ihre VM einen Port öffnen bzw. einen Endpunkt erstellen.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: ef70eb3400c7126ed0cb6dffdfd8b7208896ee40
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692656"
---
# <a name="open-ports-and-endpoints-to-a-vm-with-the-azure-cli"></a>Öffnen von Ports und Endpunkten für eine VM über die Azure-Befehlszeilenschnittstelle

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

In Azure öffnen Sie einen Port oder erstellen einen Endpunkt für einen virtuellen Computer (VM), indem Sie einen Netzwerkfilter in einem Subnetz oder einer VM-Netzwerkschnittstelle erstellen. Sie platzieren diese Filter, mit denen sowohl eingehender als auch ausgehender Datenverkehr gesteuert werden kann, in einer Netzwerksicherheitsgruppe, die an die Ressource angefügt ist, die den Datenverkehr empfängt. Wir verwenden ein gängiges Beispiel für Webdatenverkehr über Port 80. In diesem Artikel wird beschrieben, wie Sie mithilfe der Azure CLI einen Port für einen virtuellen Computer öffnen. 


Um eine Netzwerksicherheitsgruppe und Regeln erstellen zu können, muss die neueste Version der [Azure CLI](/cli/azure/install-az-cli2) installiert sein, und Sie müssen mithilfe von [az login](/cli/azure/reference-index) bei einem Azure-Konto angemeldet sein.

Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen durch Ihre eigenen Werte. Beispielparameternamen sind etwa *myResourceGroup*, *myNetworkSecurityGroup* oder *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Schnelles Öffnen eines Ports für einen virtuellen Computer
Wenn Sie in einem Entwicklungs-/Test-Szenario schnell einen Port für einen virtuellen Computer öffnen müssen, können Sie den Befehl [az vm open-port](/cli/azure/vm) verwenden. Mit diesem Befehl werden eine Netzwerksicherheitsgruppe erstellt und eine Regel hinzugefügt, die auf einen virtuellen Computer oder ein Subnetz angewendet wird. Im folgenden Beispiel wird Port *80* auf dem virtuellen Computer *myVM* in der Ressourcengruppe *myResourceGroup* geöffnet.

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Wenn Sie zusätzliche Kontrolle über die Regeln benötigen (z. B. Definieren eines Quell-IP-Adressbereichs), fahren Sie mit den weiteren Schritten in diesem Artikel fort.


## <a name="create-a-network-security-group-and-rules"></a>Erstellen einer Netzwerksicherheitsgruppe und von Regeln
Erstellen Sie die Netzwerksicherheitsgruppe mit [az network nsg create](/cli/azure/network/nsg). Im folgenden Beispiel wird die Netzwerksicherheitsgruppe *myNetworkSecurityGroup* am Standort *USA, Osten* erstellt:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Fügen Sie mit [az network nsg rule create](/cli/azure/network/nsg/rule) eine Regel hinzu, um HTTP-Datenverkehr an Ihren Webserver zuzulassen (oder passen Sie die Regel für Ihr eigenes Szenario an, beispielsweise für SSH-Zugriff oder Datenbankverbindungen). Im folgenden Beispiel wird die Regel *myNetworkSecurityGroupRule* erstellt, um TCP-Datenverkehr an Port 80 zuzulassen:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Anwenden der Netzwerksicherheitsgruppe auf den virtuellen Computer
Ordnen Sie die Netzwerksicherheitsgruppe mit [az network nic update](/cli/azure/network/nic) der Netzwerkschnittstelle des virtuellen Computers zu. Im folgenden Beispiel wird die vorhandene Netzwerkschnittstelle *myNic* Ihrer Netzwerksicherheitsgruppe *myNetworkSecurityGroup* zugewiesen:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Alternativ können Sie Ihre Netzwerksicherheitsgruppe mit [az network vnet subnet update](/cli/azure/network/vnet/subnet) dem Subnetz Ihres virtuellen Netzwerks zuordnen anstatt nur der Netzwerkschnittstelle eines einzelnen virtuellen Computers. Im folgenden Beispiel wird das vorhandene Subnetz *mySubnet* im virtuellen Netzwerk *myVnet* Ihrer Netzwerksicherheitsgruppe *myNetworkSecurityGroup* zugewiesen:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Weitere Informationen zu Netzwerksicherheitsgruppen
Mit diesen Schnellbefehlen können Sie den Datenverkehr zu Ihrem virtuellen Computer einrichten. Netzwerksicherheitsgruppen bieten eine Vielzahl erstklassiger Funktionen sowie eine differenzierte Steuerung des Ressourcenzugriffs. Weitere Informationen über die [Erstellung von Netzwerksicherheitsgruppen und ACL-Regeln erhalten Sie hier](tutorial-virtual-network.md#secure-network-traffic).

Bei hoch verfügbaren Webanwendungen sollten Sie die virtuellen Computer hinter einem Azure Load Balancer anordnen. Der Load Balancer verteilt den Datenverkehr auf virtuelle Computer, mit einer Netzwerksicherheitsgruppe, die den Datenverkehr filtert. Weitere Informationen finden Sie unter [Lastenausgleich für virtuelle Linux-Computer in Azure zum Erstellen einer hoch verfügbaren Anwendung](tutorial-load-balancer.md).

## <a name="next-steps"></a>Nächste Schritte
In diesem Beispiel haben Sie eine einfache Regel erstellt, die HTTP-Datenverkehr zulässt. Informationen zum Erstellen von detaillierteren Umgebungen finden Sie in den folgenden Artikeln:

* [Übersicht über den Azure Resource Manager](../../azure-resource-manager/management/overview.md)
* [Was ist eine Netzwerksicherheitsgruppe (NSG)?](../../virtual-network/network-security-groups-overview.md)